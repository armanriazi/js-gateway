import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  projectName,
  adminPassword,
  password
} from '../data/project.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const projectCreate = () => {
  describe('CREATE PROJECT', () => {
    let adminToken
    let adminId
    let projectId
    let project
    let token
    let userToken
    let superAdminId
    let userId
    let projectId2

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const adminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'admin'
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user'
      }

      userId = await usersService.create(userInfo)

      delete userInfo.role

      userToken = await usersService.login(userInfo)
    })

    afterAll(async () => {
      await projectsService.delete(projectId)
      await projectsService.delete(projectId2)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    beforeEach(() => {
      project = {
        name: projectName,
      }
    })

    it('creating a project completed successfully', (done) => {
      try {
        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            projectId = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: projectId,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can create projects', (done) => {
      try {
        project.name = 'sgw_2'

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(project)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            projectId2 = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: projectId2,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name is required', (done) => {
      try {
        delete project.name
        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)
          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_NAME} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name should not be empty', (done) => {
      try {
        project.name = ''

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)
          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_NAME} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name is duplicate', (done) => {
      try {

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)
          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.UNIQUE_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not create a project', (done) => {
      try {
        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${userToken}`)
          .send(project)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name must be a string', (done) => {
      try {
        project.name = 123123123

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PROJECT_NAME} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        project.field = 'kjshdfehjgashfjgashjf'

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .post('/api/projects')
          .set('Authorization', `Bearer ${expiredToken}`)
          .send(project)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .post('/api/projects')
          .send(project)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}

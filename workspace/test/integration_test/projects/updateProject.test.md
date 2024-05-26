import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  password,
  mockPrjName3,
  mockPrjName5,
} from '../data/project.js'
import projectsService from '../requiredTest/projectService.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'
import { mockApi } from '../data/service.js'

export const projectUpdate = () => {
  describe('UPDATE PROJECT', () => {
    let adminId
    let projectId
    let superAdminId
    let token
    let userToken
    let adminToken
    let userId
    let projectId2
    let project


    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin',
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        const adminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'admin',
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

        project = {
          name: mockPrjName3,
        }

        projectId = await projectsService.create(project)

        project.name = mockPrjName5

        projectId2 = await projectsService.create(project)
      } catch (error) {
        console.log(error);
      }
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
        name: 'updated_Project',
      }
    })

    it('update project completed successfully', (done) => {
      try {

        request
          .patch(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can update projects', (done) => {
      try {

        request
          .patch(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .send(project)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name must be string',(done) => {
      try {
        project.name = 7888888

        request
          .patch(`/api/projects/${projectId}`)
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
        project.api = mockApi

        request
          .patch(`/api/projects/${projectId}`)
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

    it('unauthorized user can not update a project', (done) => {
      try {
        request
          .delete(`/api/projects/${projectId}`)
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

    it('project name is duplicate',(done) => {
      try {
        project.name = 'updated_Project'

        request
          .patch(`/api/projects/${projectId2}`)
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

    it('project name should not be empty',(done) => {
      try {
        project.name = ''

        request
          .patch(`/api/projects/${projectId}`)
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

    it('projectId is not uuid', (done) => {
      try {
        let wrongProjectId = 'kjh23j4h2jh34'

        request
          .patch(`/api/projects/${wrongProjectId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(project)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('project name is required ',(done) => {
      try {
        delete project.name

        request
          .patch(`/api/projects/${projectId}`)
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

    it('login needed', (done) => {
      try {
        request
          .patch(`/api/projects/${projectId}`)
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

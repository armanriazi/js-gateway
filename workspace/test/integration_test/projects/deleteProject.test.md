import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'


import {
  adminPassword,
  password,
  mockPrjName,
} from '../data/project.js'
import projectsService from '../requiredTest/projectService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const projectDelete = () => {
  describe('DELETE PROJECT ', () => {
    let adminId
    let projectId
    let superAdminId
    let token
    let userToken
    let adminToken
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

      const project = {
        name: mockPrjName,
      }

      projectId = await projectsService.create(project)

      const project2 = {
        name: 'mockPrjName',
      }
      projectId2 = await projectsService.create(project2)
    })

    afterAll(async () => {
      await projectsService.delete(projectId)
      await projectsService.delete(projectId2)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    it('project has been removed successfully', (done) => {
      try {
        request
          .delete(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${token}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 1,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can delete projects', (done) => {
      try {
        request
          .delete(`/api/projects/${projectId2}`)
          .set('Authorization', `Bearer ${adminToken}`)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: 1,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not delete a project', (done) => {
      try {
        request
          .delete(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${userToken}`)
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

    it('projectId is not found',(done) => {
      try {
        let wrongProjectId = 'kjh23j4h2jh34'

        request
          .delete(`/api/projects/${wrongProjectId}`)
          .set('Authorization', `Bearer ${token}`)
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

    it('projectId is not uuid',(done) => {
      try {
        let wrongProjectId = 'kjh23j4h2jh34'

        request
          .delete(`/api/projects/${wrongProjectId}`)
          .set('Authorization', `Bearer ${token}`)
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

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'
        
        request
          .delete(`/api/projects/${projectId}`)
          .set('Authorization', `Bearer ${expiredToken}`)
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
          .delete(`/api/projects/${projectId}`)
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
